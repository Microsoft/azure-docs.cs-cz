---
title: Návrhu hybridní identity - strategii životního cyklu přijetí Azure | Microsoft Docs
description: Pomáhá, definování úloh správy hybridní identity podle možností, které jsou k dispozici pro jednotlivé fáze životního cyklu.
documentationcenter: ''
services: active-directory
author: billmath
manager: mtillman
editor: ''
ms.assetid: 420b6046-bd9b-4fce-83b0-72625878ae71
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/30/2018
ms.component: hybrid
ms.author: billmath
ms.custom: seohack1
ms.openlocfilehash: 911e65719bff0a86e3280482296bbb320fd30377
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/05/2018
ms.locfileid: "34801826"
---
# <a name="determine-hybrid-identity-lifecycle-adoption-strategy"></a>Určení strategii přijetí životního cyklu hybridní identity
V této úloze definujete strategie správy identit pro hybridní identity řešení splňovat obchodní požadavky definovaný v [určit úlohy správy hybridní identity](active-directory-hybrid-identity-design-considerations-hybrid-id-management-tasks.md).

Jak definovat úkoly správy hybridní identity podle identity začátku do konce životní cyklus uvedené výše v tomto kroku, budete muset zvážit možnosti, které jsou k dispozici pro jednotlivé fáze životního cyklu.

## <a name="access-management-and-provisioning"></a>Správa přístupu a zřizování
Řešení pro správu přístupu k funkčním účet vaší organizace můžete sledovat přesněji kdo má přístup k informacích celé organizace.

Řízení přístupu je důležité funkce systému zřizování centralizovaný, jeden bod. Kromě chránit citlivé informace, řízení přístupu vystavit existující účty, které mají neschválených oprávnění nebo se již nebude nutné. Pokud chcete řídit zastaralé účty systému odkazy společně účet informace pro zřizování s autoritativní informace o uživatelích, kteří vlastní účty. Informace o identitě autoritativní uživatele se obvykle udržuje v databáze a adresáře lidských zdrojů.

Účty v sofistikované podniky zahrnují stovky parametry, které definují autority a tyto podrobnosti můžete ovlivnit pomocí systému zřizování. Je možné zjistit noví uživatelé s daty, které poskytujete z autoritativní zdroj. Možnost přístupu k žádosti o schválení zahájí procesů, které schválit (nebo odmítnout) zřizování pro jejich prostředků.

| Fáze životního cyklu správy | Místní | Cloud | Hybridní |
| --- | --- | --- | --- |
| Správa účtů a zřizování |Pomocí role serveru Active Directory® Domain Services (AD DS) můžete vytvářet škálovatelnou, zabezpečenou a zvladatelnou infrastrukturu pro správu uživatelů a prostředků a poskytovat podporu pro práci s adresáři aplikace, jako je Microsoft® Exchange Server. <br><br> [Můžete zřídit skupiny ve službě AD DS prostřednictvím Identity manager](https://technet.microsoft.com/library/ff686261.aspx) <br>[ Můžete zřídit uživatelů ve službě AD DS](https://technet.microsoft.com/library/ff686263.aspx) <br><br> Správci vám pomůže spravovat přístup uživatelů k sdílených prostředků z bezpečnostních důvodů řízení přístupu. Ve službě Active Directory, je Správa řízení přístupu na úrovni objektu podle nastavení různé úrovně přístupu nebo oprávnění k objektům, jako je například Úplné řízení, zápisu, čtení nebo žádný přístup. Jak budou různí uživatelé definuje řízení přístupu ve službě Active Directory můžete použít objekty služby Active Directory. Ve výchozím nastavení jsou nejbezpečnější nastavení nastavit oprávnění u objektů ve službě Active Directory. |Je nutné vytvořit účet pro každého uživatele, kteří budou přistupovat ke cloudové službě Microsoftu. Můžete také změnit uživatelské účty nebo je odstranit, když jste už nepotřebují. Ve výchozím nastavení uživatelé nebudou mít oprávnění správce, ale můžete volitelně přiřadit. Další informace najdete v tématu [Správa uživatelů ve službě Azure AD](active-directory-create-users.md). <br><br> V rámci Azure Active Directory mezi hlavní funkce je schopnost spravovat přístup k prostředkům. Tyto prostředky můžou být součástí adresáři, jako v případě oprávnění ke správě objektů pomocí rolí v adresáři nebo prostředky, které jsou externí vzhledem k adresáři, jako je například aplikace SaaS, služby Azure a webů služby SharePoint nebo místních prostředků. <br><br> Přístup center z Azure služby Active Directory je řešení pro správu skupiny zabezpečení. Vlastník prostředku (nebo správce adresáře) můžete přiřadit skupiny k poskytování určité přístupu k prostředkům, které vlastní. Členové skupiny se poskytnout přístup a vlastníka prostředku můžete udělit oprávnění ke správě seznamu členů skupiny, která někdo jiný – například Správce oddělení nebo technickou podporu správce<br> <br> Správa skupin v části služby Azure AD, poskytuje další informace o správě přístupu pomocí skupin. |Rozšíření do cloudu prostřednictvím synchronizace a federaci identit služby Active Directory |

## <a name="role-based-access-control"></a>Řízení přístupu na základě role
Přístup na základě rolí používá role (RBAC) řídit a zřizování zásad k vyhodnocení, testování a vynucovat podnikové procesy a pravidla pro udělení přístupu uživatelům. Klíče správci vytvářet zásady zřizování a přiřadit uživatele k rolím a který definovat sadu oprávnění k prostředkům pro tyto role. RBAC rozšiřuje řešení správy identit pro používání procesů založených na softwaru a snížit ruční zásah uživatele v procesu zřizování.
Azure AD RBAC umožňuje společnosti umožní omezit počet operací, které konkrétního můžete provést po má přístup k portálu Azure. Správci IT ca pomocí RBAC pro řízení přístupu na portál delegovat přístup pomocí následujících postupů správy přístupu:

* **Přiřazení role na základě skupiny**: můžete přiřadit přístup ke skupinám Azure AD, které mohou být synchronizovány z místní služby Active Directory. Díky tomu můžete využít stávající investice, které vaše organizace má provést v nástrojů a procesů pro správu skupin. Můžete také použít funkci delegované skupiny správy služby Azure AD Premium.
* **Využívání, které jsou součástí role v Azure**: tři role je možné použít – vlastník, Přispěvatel a čtečky, ujistěte se, že uživatelé a skupiny mají oprávnění k provádět jenom úkoly, které potřebují svou práci.
* **Podrobné přístup k prostředkům**: můžete přiřadit role pro uživatele a skupiny pro určitý odběr, skupinu prostředků nebo jednotlivých prostředků Azure, jako je například na webu nebo v databázi. Tímto způsobem zajistíte, že uživatelé mají přístup ke všem prostředkům, které potřebují a bez přístupu k prostředkům, které není potřeba spravovat.

## <a name="provisioning-and-other-customization-options"></a>Zřizování a další možnosti přizpůsobení
Váš tým můžete použít požadavky a obchodní plány se rozhodnout, kolik k přizpůsobení řešení identit. Velký podnik může například vyžadovat postupné zavádění plán pro pracovní postupy a vlastní adaptéry, které je založené na časovou osu pro přírůstkově zřizování aplikací široce používaných v zeměpisných. Pro dva nebo více aplikací zřídit v rámci celé organizace, po úspěšném testování můžete získat z jiného vlastního nastavení plánu. Interakce uživatele aplikace se dají přizpůsobit a postupy pro zřizování prostředků může změnit tak, aby dokázala pojmout automatického zřizování.

Můžete zrušení zřízení, který Pokud chcete odebrat, služba nebo komponenta. Zrušení zřízení účtu například znamená, že byl účet odstraněn ze zdroje.

Model hybridní zřizování prostředků kombinuje požadavku a na základě rolí přístupů, které jsou podporovány službou Azure AD. Pro podmnožinu zaměstnanců nebo spravované systémy firma chtít automatizovat přístup s přiřazením na základě rolí. Firma může také zpracovat všechny požadavky na přístup nebo výjimky prostřednictvím modelu na základě požadavku. Některé podniky mohou začínat ruční přiřazení a momentální směrem k hybridní model, se záměr nasazení plně na základě rolí v budoucnosti.

Jiných společností mohou najít nepraktické pro obchodních důvodů, proč k dosažení úplné zřizování na základě rolí a cíle přístup hybridní jako požadovaného cíle. Stále jiných společností mohou být s zajišťování pouze na základě požadavku a investice další úsilí můžete definovat a spravovat zásady zřizování na základě rolí, automatizované, aby.

## <a name="license-management"></a>Správa licencí
Správa licencí na základě skupiny ve službě Azure AD umožňuje správci přiřadit uživatele do skupiny zabezpečení a Azure AD automaticky přiřadí licence všem členům skupiny. Pokud uživatel je následně přidány do nebo ze skupiny odstraněni, licence bude automaticky přiřazen nebo odebrat podle potřeby.

Můžete použít skupiny, kterou synchronizujete z místní AD nebo spravovat ve službě Azure AD. To párování s Azure AD premium Samoobslužná správa skupiny můžete snadno delegovat přiřazení licence k příslušné vedoucím pracovníkům. Můžete si být jistí, že jsou problémy, jako je v konfliktu licencí a chybějící data o umístění automaticky seřazené.

## <a name="self-regulating-user-administration"></a>Samoobslužné regulační Správa uživatele
Jakmile vaši organizaci zřízení prostředků mezi všechny interní organizace, můžete implementovat samoobslužné regulační funkce správy uživatelů. Výhody a výhody zřizování uživatelů můžou realizovat přes její hranice. V tomto prostředí je změna stav uživatele, odrazí v přístupová práva přes hranice organizace a zeměpisných automaticky. Můžete snížit náklady na zřizování a zjednodušit procesy přístup a schválení. Implementace rozpoznává úplné potenciálně implementaci řízení přístupu na základě rolí pro správu přístupu začátku do konce ve vaší organizaci. Můžete snížit náklady na správu prostřednictvím automatizované postupy pro řídících zřizování uživatelů. Můžete zlepšit zabezpečení tím, že automatizuje vynucení zásad zabezpečení a zjednodušit a centralizovat správu životního cyklu uživatele a zřizování prostředků pro plnění velké uživatele.

> [!NOTE]
> Další informace najdete v tématu Nastavení služby Azure AD pro samoobslužnou správu přístupu aplikace
> 
> 

Na základě licencí (nárok na základě) Azure AD služby pracovní podle aktivaci předplatného v klientovi directory nebo služby Azure AD. Jakmile bude předplatné aktivní možnosti služby můžete spravují správci nástroje directory nebo služby a používá licencovaní uživatelé. 

## <a name="integration-with-other-3rd-party-providers"></a>Integrace s poskytovatelé další 3.

Azure Active Directory poskytuje jednotné přihlašování a rozšířeného zabezpečení aplikace přístup k tisícům SaaS a místní webové aplikace. Další informace najdete v tématu [integrace aplikací s Azure Active Directory](../active-directory/develop/active-directory-integrating-applications.md)

## <a name="define-synchronization-management"></a>Definování správy synchronizace
Integrace místních adresářů se službou Azure AD zvyšuje produktivitu uživatelů tím, že jim poskytuje společnou identitu pro přístup ke cloudovým i místním prostředkům. Díky této integraci můžete využít výhod těchto uživatelů a organizací:

* Organizace můžou poskytovat uživatelům jednotnou identitu hybridní přes místní nebo cloudové služby využívají Windows Server Active Directory a následným připojením ke službě Azure Active Directory.
* Správci můžou poskytovat podmíněný přístup na základě prostředků aplikace, zařízení a identity uživatele, umístění v síti a vícefaktorového ověřování.
* Uživatelé mohou využívat jejich společnou identitu prostřednictvím účty ve službě Azure AD k Office 365, Intune, aplikace SaaS a aplikace třetích stran.
* Vývojáři mohou vytvářet aplikace, které využívají společného modelu identity integrace aplikací do služby Active Directory v místě nebo Azure pro cloudové aplikace

Na následujícím obrázku je příkladem široký přehled o procesu synchronizace identit.

![](./media/hybrid-id-design-considerations/identitysync.png)

Proces synchronizace identit

Projděte si následující tabulce můžete porovnat možnosti synchronizace:

| Možnost správy synchronizace | Výhody | Nevýhody |
| --- | --- | --- |
| Na základě synchronizace (prostřednictvím DirSync nebo službu AADConnect.) |Uživatelé a skupiny synchronizované z místní a cloudové <br>  **Řízení zásad**: zásady účtů lze nastavit pomocí služby Active Directory, která umožňuje správci schopnost spravovat zásady pro hesla, pracovní stanice, omezení, se na více systémů zámek ovládací prvky a další, bez nutnosti provádět další úkoly v v cloudu.  <br>  **Řízení přístupu**: můžete omezit přístup ke cloudové službě, tak, aby služby je možné přistupovat prostřednictvím podnikovém prostředí, prostřednictvím online servery nebo oba. <br>  Snížená volání podpory: Pokud uživatelé používají méně hesla pamatovat, jsou méně pravděpodobné, že je zapomenete. <br>  Zabezpečení: Identity uživatelů a informace jsou chráněny, protože všechny servery a služby jsou používány v jednotné přihlašování, řídí se hlavním a řídí místně. <br>  Podpora pro silné ověřování: silného ověřování (také nazývané dvoufaktorové ověřování) můžete použít s cloudovou službou. Ale pokud používáte silné ověřování, musíte použít jednotné přihlašování. | |
| Na základě Federation (prostřednictvím služby AD FS) |Povolit pomocí služby tokenů zabezpečení (STS). Při konfiguraci služby tokenů zabezpečení zajistit přístup přihlášení ke cloudové službě společnosti Microsoft, bude vytvářet federovaného vztahu důvěryhodnosti mezi vaší místní služby tokenů zabezpečení a federované domény, kterou jste určili v klientovi služby Azure AD. <br> Umožňuje koncovým uživatelům používat stejnou sadu pověření pro přístup k více prostředkům <br>koncoví uživatelé nemají k udržování více sad přihlašovací údaje. Ještě muset uživatelé zadat své přihlašovací údaje pro každé z nich zúčastněných prostředků., scénáře B2B a B2C podporována. |Vyžaduje specializované pracovníky pro nasazení a údržby vyhrazené místní servery služby AD FS. Existují omezení použití silné ověřování, pokud máte v úmyslu používat službu AD FS pro vaši službu tokenů zabezpečení. Další informace najdete v tématu [konfigurace rozšířené možnosti pro službu AD FS 2.0](http://go.microsoft.com/fwlink/?linkid=235649). |

> [!NOTE]
> Další informace najdete v tématu [integrace místních identit s Azure Active Directory](active-directory-aadconnect.md).
> 
> 

## <a name="see-also"></a>Viz také
[Přehled aspektů návrhu](active-directory-hybrid-identity-design-considerations-overview.md)

