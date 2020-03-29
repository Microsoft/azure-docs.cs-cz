---
title: Návrh hybridní identity – strategie pro přijetí životního cyklu Azure | Dokumenty společnosti Microsoft
description: Pomáhá definovat úlohy správy hybridní identity podle možností dostupných pro každou fázi životního cyklu.
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
ms.openlocfilehash: 85f600c8bd46e699e80bf7b596574dc01467ef79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67109318"
---
# <a name="determine-hybrid-identity-lifecycle-adoption-strategy"></a>Určení strategie přechodu na hybridní identitu
V této úloze definujete strategii správy identit pro řešení hybridní identity tak, aby splňovala obchodní požadavky definované v [části Určení úloh správy hybridní identity](plan-hybrid-identity-design-considerations-hybrid-id-management-tasks.md).

Chcete-li definovat úlohy správy hybridní identity podle životního cyklu identity začátku do konce, který je uveden dříve v tomto kroku, budete muset zvážit možnosti, které jsou k dispozici pro každou fázi životního cyklu.

## <a name="access-management-and-provisioning"></a>Správa přístupu a zřizování
Díky dobrému řešení pro správu přístupu k účtu může vaše organizace přesně sledovat, kdo má přístup k jakým informacím v celé organizaci.

Řízení přístupu je kritická funkce centralizovaného jednobodového zřizovacího systému. Kromě ochrany citlivých informací zveřejňují ovládací prvky přístupu existující účty, které mají neschválené oprávnění nebo již nejsou nutné. Chcete-li řídit zastaralé účty, systém zřizování propojí informace o účtu s autoritativními informacemi o uživatelích, kteří účty vlastní. Autoritativní informace o identitě uživatele jsou obvykle uchovávány v databázích a adresářích lidských zdrojů.

Účty v sofistikovaných IT podnicích obsahují stovky parametrů, které definují autority, a tyto podrobnosti mohou být řízeny vaším systémem zřizování. Noví uživatelé mohou být identifikováni s daty, která zadáte z autoritativního zdroje. Funkce schválení žádosti o přístup iniciuje procesy, které pro ně schvalují (nebo odmítají) zřizování prostředků.

| Fáze správy životního cyklu | V areálu | Cloud | Hybridní |
| --- | --- | --- | --- |
| Správa a zřizování účtů |Pomocí role serveru AD DS (Active Directory® Domain Services) můžete vytvářet škálovatelnou, zabezpečenou a zvladatelnou infrastrukturu pro správu uživatelů a prostředků a poskytovat podporu pro aplikace pro práci s adresáři, jako je Microsoft® Exchange Server. <br><br> [Skupiny ve službě AD DS můžete zřazovat prostřednictvím správce identit.](https://technet.microsoft.com/library/ff686261.aspx) <br>[Uživatele ve spořite ve spo.a.,](https://technet.microsoft.com/library/ff686263.aspx) <br><br> Správci mohou pomocí řízení přístupu spravovat přístup uživatelů ke sdíleným prostředkům pro účely zabezpečení. Ve službě Active Directory je řízení přístupu spravováno na úrovni objektu nastavením různých úrovní přístupu nebo oprávnění k objektům, jako je například Úplné řízení, Zápis, Čtení nebo Žádný přístup. Řízení přístupu ve službě Active Directory definuje, jak mohou různí uživatelé používat objekty služby Active Directory. Ve výchozím nastavení jsou oprávnění k objektům ve službě Active Directory nastavena na nejbezpečnější nastavení. |Musíte vytvořit účet pro každého uživatele, který bude mít přístup ke cloudové službě Microsoft. Uživatelské účty můžete také změnit nebo je odstranit, když už nejsou potřeba. Ve výchozím nastavení nemají uživatelé oprávnění správce, ale můžete je volitelně přiřadit. <br><br> V rámci služby Azure Active Directory je jednou z hlavních funkcí možnost spravovat přístup k prostředkům. Tyto prostředky mohou tvořit součást adresáře jako v případě oprávnění ke správě objektů prostřednictvím rolí v adresáři, nebo prostředků, které jsou pro adresář externí, jako jsou aplikace SaaS, služby Azure a sharepointové weby nebo místní prostředky. <br><br> V centru řešení správy přístupu služby Azure Active Directory je skupina zabezpečení. Vlastník prostředku (nebo správce adresáře) může přiřadit skupinu poskytující určité přístupové právo k prostředkům, které vlastní. Členům skupiny bude poskytnut přístup a vlastník zdroje může delegovat právo spravovat seznam členů skupiny na někoho jiného – například manažera oddělení nebo správce technické podpory.<br> <br> Správa skupin v části Azure AD, poskytuje další informace o správě přístupu prostřednictvím skupin. |Rozšíření identit služby Active Directory do cloudu prostřednictvím synchronizace a federace |

## <a name="role-based-access-control"></a>Řízení přístupu na základě role
Řízení přístupu na základě rolí (RBAC) používá role a zřizování zásad y vyhodnotit, otestovat a vynutit vaše obchodní procesy a pravidla pro udělení přístupu k uživatelům. Klíčoví správci vytvářejí zásady zřizování a přiřazují uživatele k rolím a definují sady nároků na prostředky pro tyto role. RBAC rozšiřuje řešení správy identit na použití softwarových procesů a snížit interakci uživatelské příručky v procesu zřizování.
Azure AD RBAC umožňuje společnosti omezit počet operací, které jednotlivec může provést, jakmile budou mít přístup k portálu Azure. Pomocí RBAC k řízení přístupu k portálu správci IT delegují přístup pomocí následujících přístupů pro správu přístupu:

* **Přiřazení rolí na základě skupiny:** Můžete přiřadit přístup ke skupinám Azure AD, které lze synchronizovat z místní služby Active Directory. To vám umožní využít stávající investice, které vaše organizace provedla v nástrojích a procesech pro správu skupin. Můžete také použít funkci správy delegované skupiny Azure AD Premium.
* **Využijte integrované role v Azure**: Můžete použít tři role – vlastníka, přispěvatele a čtenáře, abyste zajistili, že uživatelé a skupiny budou mít oprávnění provádět jenom úkoly, které potřebují ke své práci.
* **Podrobný přístup k prostředkům**: Můžete přiřadit role uživatelům a skupinám pro konkrétní předplatné, skupinu prostředků nebo jednotlivé prostředky Azure, jako je web nebo databáze. Tímto způsobem můžete zajistit, aby uživatelé měli přístup ke všem prostředkům, které potřebují, a žádný přístup k prostředkům, které nepotřebují spravovat.

## <a name="provisioning-and-other-customization-options"></a>Zřizování a další možnosti přizpůsobení
Váš tým může pomocí obchodních plánů a požadavků rozhodnout, jak moc přizpůsobit řešení identity. Velký podnik může například vyžadovat plán postupného zavádění pracovních postupů a vlastních adaptérů, který je založen na časové ose pro postupné zřizování aplikací, které jsou široce používány napříč zeměpisnými oblastmi. Jiný plán vlastního nastavení může stanovit, že po úspěšném testování budou zřízeny dvě nebo více aplikací v celé organizaci. Interakce mezi uživatelem a aplikací lze přizpůsobit a postupy pro zřizování prostředků může být změněntak, aby vyhovovaly automatizované zřizování.

Odebrání služby nebo součásti můžete zrušit. Například zrušení zřízení účtu znamená, že účet je odstraněn z prostředku.

Hybridní model zřizování prostředků kombinuje přístupy založené na požadavcích a rolích, které jsou podporovány službou Azure AD. Pro podmnožinu zaměstnanců nebo spravovaných systémů může firma chtít automatizovat přístup pomocí přiřazení založeného na rolích. Firma může také zpracovávat všechny ostatní požadavky na přístup nebo výjimky prostřednictvím modelu založeného na požadavcích. Některé firmy mohou začít s ručním přiřazením a vyvíjet se směrem k hybridnímu modelu s úmyslem nasazení založeného na plně rolích v budoucnu.

Pro jiné společnosti může být z obchodních důvodů nepraktické dosáhnout úplného zřizování založeného na rolích a cílit na hybridní přístup jako na hledaný cíl. Ještě ostatní společnosti mohou být spokojeni pouze s zřizování založené na požadavcích a nechtějí investovat další úsilí do definování a správy zásad automatizovaného zřizování založeného na rolích.

## <a name="license-management"></a>Správa licencí
Správa licencí založená na skupinách ve službě Azure AD umožňuje správcům přiřadit uživatele ke skupině zabezpečení a Služba Azure AD automaticky přiřazuje licence všem členům skupiny. Pokud je uživatel následně přidán do skupiny nebo z ní odebrán, licence bude automaticky přiřazena nebo odebrána podle potřeby.

Můžete použít skupiny, které synchronizujete z místního služby AD nebo můžete spravovat ve službě Azure AD. Párování s Azure AD premium Samoobslužné group management můžete snadno delegovat přiřazení licencí na příslušné osoby s rozhodovací pravomocí. Můžete si být jisti, že problémy, jako jsou konflikty licencí a chybějící data o poloze, jsou automaticky vyřešeny.

## <a name="self-regulating-user-administration"></a>Samoregulační správa uživatelů
Když vaše organizace začne zřizuje prostředky ve všech interních organizacích, implementujete samoregulační funkci správy uživatelů. Můžete realizovat výhody a výhody zřizování uživatelů přes hranice organizace. V tomto prostředí se změna stavu uživatele automaticky projeví v přístupových právech napříč hranicemi organizace a zeměpisnými oblastmi. Můžete snížit náklady na zřizování a zefektivnit procesy přístupu a schvalování. Implementace realizuje plný potenciál implementace řízení přístupu na základě rolí pro správu přístupu od konce do konce ve vaší organizaci. Můžete snížit administrativní náklady prostřednictvím automatizovaných postupů pro řízení zřizování uživatelů. Zabezpečení můžete zlepšit automatizací vynucení zásad zabezpečení a zefektivnit a centralizovat správu životního cyklu uživatelů a zřizování prostředků pro velké uživatelské skupiny.

> [!NOTE]
> Další informace najdete v tématu Nastavení Azure AD pro samoobslužnou správu přístupu k aplikacím
> 
> 

Služby Azure AD založené na licencích (založené na oprávněních) fungují aktivací předplatného v tenantovi adresáře a služby Azure AD. Jakmile je předplatné aktivní, mohou být možnosti služby spravovány správci adresářů/služeb a používány licencovanými uživateli. 

## <a name="integration-with-other-3rd-party-providers"></a>Integrace s dalšími poskytovateli třetích stran

Azure Active Directory poskytuje jednotné přihlašování a rozšířené zabezpečení přístupu k aplikacím tisícům aplikací SaaS a místních webových aplikací. Další informace najdete [v tématu Integrace aplikací s Azure Active Directory](../develop/quickstart-v1-integrate-apps-with-azure-ad.md)

## <a name="define-synchronization-management"></a>Definovat správu synchronizace
Integrace místních adresářů se službou Azure AD zvyšuje produktivitu uživatelů tím, že jim poskytuje společnou identitu pro přístup ke cloudovým i místním prostředkům. Díky této integraci mohou uživatelé a organizace využít následující možnosti:

* Organizace mohou uživatelům poskytovat společnou hybridní identitu napříč místními nebo cloudovými službami využívajícími službu Windows Server Active Directory a poté se připojit ke službě Azure Active Directory.
* Správci mohou poskytovat podmíněný přístup na základě prostředků aplikace, identity zařízení a uživatele, umístění v síti a vícefaktorového ověřování.
* Uživatelé můžou využívat svou společnou identitu prostřednictvím účtů ve službě Azure AD k Office 365, Intune, aplikacím SaaS a aplikacím třetích stran.
* Vývojáři můžou vytvářet aplikace, které využívají společný model identity a integrují aplikace do místní služby Active Directory nebo Azure pro cloudové aplikace.

Následující obrázek má příklad procesu synchronizace identity na vysoké úrovni.

![Sync](./media/plan-hybrid-identity-design-considerations/identitysync.png)

Proces synchronizace identity

V následující tabulce můžete porovnat možnosti synchronizace:

| Možnost správy synchronizace | Výhody | Nevýhody |
| --- | --- | --- |
| Založené na synchronizaci (přes DirSync nebo AADConnect) |Uživatelé a skupiny synchronizované z místního a cloudu <br>  **Ovládací prvek zásady**: Zásady účtů lze nastavit prostřednictvím služby Active Directory, která umožňuje správci spravovat zásady hesel, pracovní stanici, omezení, ovládací prvky uzamčení a další, aniž by bylo třeba provádět další úlohy v cloudu.  <br>  **Řízení přístupu**: Můžete omezit přístup ke cloudové službě tak, aby služby lze přistupovat prostřednictvím podnikového prostředí, prostřednictvím online serverů nebo obojí. <br>  Snížená volání podpory: Pokud mají uživatelé méně hesel k zapamatování, je méně pravděpodobné, že je zapomenou. <br>  Zabezpečení: Identity a informace uživatelů jsou chráněny, protože všechny servery a služby používané při jednotném přihlašování jsou řízeny a řízeny místně. <br>  Podpora silného ověřování: S cloudovou službou můžete použít silné ověřování (nazývané se také dvoufaktorové ověřování). Pokud však používáte silné ověřování, musíte použít jednotné přihlašování. | |
| Na základě federace (prostřednictvím služby AD FS) |Povoleno službou Token Security Token (STS). Když nakonfigurujete STS tak, aby poskytoval přístup k jednotnému přihlášení pomocí cloudové služby Microsoftu, vytvoříte federovaný vztah důvěryhodnosti mezi místním systémem STS a federovovodanou doménou, kterou jste zadali v tenantovi Azure AD. <br> Umožňuje koncovým uživatelům používat stejnou sadu pověření k získání přístupu k více prostředkům. <br>koncoví uživatelé nemusí udržovat více sad pověření. Přesto uživatelé musí poskytnout svá pověření pro každý z zúčastněných prostředků., B2B a B2C scénáře podporované. |Vyžaduje specializovaný personál pro nasazení a údržbu vyhrazených místních serverů Služby AD FS. Pokud plánujete používat službu AD FS pro váš STS, existují omezení týkající se použití silného ověřování. Další informace naleznete [v tématu Konfigurace rozšířených možností pro službu AD FS 2.0](https://go.microsoft.com/fwlink/?linkid=235649). |

> [!NOTE]
> Další informace najdete [v tématu Integrace místních identit pomocí služby Azure Active Directory](whatis-hybrid-identity.md).
> 
> 

## <a name="see-also"></a>Viz také
[Přehled aspekty návrhu](plan-hybrid-identity-design-considerations-overview.md)

