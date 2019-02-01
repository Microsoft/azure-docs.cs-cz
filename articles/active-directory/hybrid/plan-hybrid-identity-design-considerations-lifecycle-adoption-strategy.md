---
title: Hybridní identita Návrh – strategie přijetí životního cyklu Azure | Dokumentace Microsoftu
description: Pomáhá definovat úkoly správy hybridní identity podle možností dostupných pro každou fázi životního cyklu.
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
ms.openlocfilehash: 856b392c1d17f0cd8f08a550703e4159801dcb71
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55487268"
---
# <a name="determine-hybrid-identity-lifecycle-adoption-strategy"></a>Určení strategie přijetí životního cyklu hybridní identity
V této úloze budete definovat strategie správy identit pro vaše řešení hybridní identity plnění obchodních požadavků, který jste definovali v [určit úlohy správy hybridních identit](plan-hybrid-identity-design-considerations-hybrid-id-management-tasks.md).

Chcete-li definovat úkoly správy hybridní identity podle identity začátku do konce cyklu dříve v tomto kroku, je nutné zvážit možnosti dostupné pro každou fázi životního cyklu.

## <a name="access-management-and-provisioning"></a>Správa a zřizování přístupu
Pomocí řešení správy přístupu vhodné účet vaší organizace můžete sledovat přesně kdo má přístup k informací v organizaci.

Řízení přístupu je důležité funkce systému zřizování centralizované, jeden bod. Kromě chránících citlivé informace, řízení přístupu k vystavení existující účty, které mají neschválených autorizace nebo jsou již nejsou potřebné. Chcete-li řídit zastaralé účty, zřízení systému odkazy společně informací o účtu s autoritativní informace o uživatelích, kteří vlastní účty. Informace o identitě autoritativní uživatele je obvykle udržuje v databázích a adresáře lidských zdrojů.

Účty v podnicích sofistikované IT zahrnují stovky parametry, které definují autority a tyto údaje mohou být řízena zřizování systému. Noví uživatelé lze identifikovat s daty, které poskytujete z autoritativního zdroje. Možnost přístupu k žádosti o schválení zahájí procesy, které schválit (nebo odmítnout) zřizování prostředků pro ně.

| Fáze životního cyklu správy | V místním prostředí | Cloud | Hybridní |
| --- | --- | --- | --- |
| Správa účtů a zřizování |Pomocí role serveru Active Directory® Domain Services (AD DS) můžete vytvářet škálovatelnou, zabezpečenou a zvladatelnou infrastrukturu pro správu uživatelů a prostředků a poskytovat podporu pro práci s adresáři aplikace, jako je Microsoft® Exchange Server. <br><br> [Můžete zřídit skupiny ve službě AD DS prostřednictvím Identity Manageru](https://technet.microsoft.com/library/ff686261.aspx) <br>[ Můžete zřídit uživatele ve službě AD DS](https://technet.microsoft.com/library/ff686263.aspx) <br><br> Správci mohou používat řízení přístupu ke správě přístupu uživatelů ke sdíleným prostředkům z bezpečnostních důvodů. Ve službě Active Directory, řízení přístupu řídí na úrovni objektu pomocí nastavení různé úrovně přístupu nebo oprávnění k objektům, jako je například Úplné řízení, zápisu, čtení nebo žádný přístup. Řízení přístupu ve službě Active Directory definuje, jak různí uživatelé pomocí objektů služby Active Directory. Ve výchozím nastavení jsou oprávnění k objektům ve službě Active Directory nastavit nejbezpečnější nastavení. |Je nutné vytvořit účet pro každého uživatele, který bude mít přístup k cloudové službě Microsoftu. Můžete také změnit uživatelské účty nebo je odstranit, pokud jste už nepotřebujete. Ve výchozím nastavení uživatelé nemají oprávnění správce, ale můžete je volitelně přiřadit. <br><br> V rámci služby Azure Active Directory jednou z hlavních funkcí je schopnost spravovat přístup k prostředkům. Tyto prostředky mohou tvořit součást adresáře jako v případě oprávnění ke správě objektů prostřednictvím rolí v adresáři, nebo prostředků, které jsou pro adresář externí, jako jsou aplikace SaaS, služby Azure a sharepointové weby nebo místní prostředky. <br><br> V System center z Azure Active Directory pro přístup k řešení pro správu je skupina zabezpečení. Vlastník prostředku (nebo správce adresáře) může přiřadit skupinu poskytující určité přístupové právo k prostředkům, které vlastní. Členové skupiny, poskytneme vám přístup a vlastník prostředku můžete udělit oprávnění ke správě seznamu členů skupiny, která někdo jiný – například vedoucí oddělení nebo správce technické podpory<br> <br> Správa skupin v části Azure AD poskytuje další informace o správě přístupu pomocí skupin. |Rozšíření služby Active Directory identit do cloudu prostřednictvím synchronizace a federace |

## <a name="role-based-access-control"></a>Řízení přístupu na základě role
Přístup na základě rolí používá role (RBAC) řídit a zřizování zásad k vyhodnocení, testování a vynucování obchodních procesů a pravidel pro udělení přístupu uživatelům. Klíče správci vytvořit zásady zřizování a přiřazení uživatelů k rolím a, které definují sadu oprávnění k prostředkům pro tyto role. RBAC rozšiřuje řešení správy identit použijte procesy na software a snížit ručního zásahu uživatele v procesu zřizování.
Azure AD RBAC umožňuje společnosti chcete omezit počet operací, které jednotlivec můžete dělat, když má přístup k webu Azure portal. Pomocí RBAC pro řízení přístupu k portálu správce IT certifikační autority delegovat přístup pomocí následujících postupů správy přístupu:

* **Přiřazení role na základě skupin**: Přístup můžete přiřadit skupinám Azure AD, které se synchronizují z vaší místní služby Active Directory. To umožňuje využít stávající investice, které vaše organizace provedla v nástrojů a procesů pro správu skupin. Můžete také použít funkci delegované skupiny pro správu služby Azure AD Premium.
* **Využijte vestavěné role v Azure**: Můžete použít tři role – vlastník, Přispěvatel a Čtenář, ujistěte se, že uživatelé a skupiny mají oprávnění pouze úlohy, které potřebují ke své práci.
* **Granulární přístup k prostředkům**: Můžete je přiřadit role uživatelů a skupin pro konkrétní předplatné, skupinu prostředků nebo jednotlivých prostředků Azure, jako je například na webu nebo v databázi. Tímto způsobem můžete zajistit, aby uživatelé měli přístup ke všem prostředkům, které potřebují a bez přístupu k prostředkům, které není potřeba spravovat.

## <a name="provisioning-and-other-customization-options"></a>Zřizování a další možnosti vlastního nastavení
Váš tým může použít obchodní plány a požadavky se rozhodnout, kolik chcete přizpůsobit řešení identit. Například velký podnik může vyžadovat postupné zavedení plánu pro pracovní postupy a vlastní adaptéry, které je založená na časové ose pro přírůstkové zřizování aplikací, které se běžně používají napříč geografickými oblastmi. Pro dva nebo více aplikací zřídit napříč celou organizací, po úspěšném otestování může poskytovat jiný plán přizpůsobení. Interakce uživatele – aplikace se dají přizpůsobit a postupy pro zřizování prostředků může být změněn tak, aby vyhovovaly automatického zřizování.

Můžete zrušit zřízení k odebrání služby nebo komponenty. Zrušení zřízení účtu například znamená, že účet je odstraněn z prostředku.

Hybridního modelu zřizování prostředků kombinuje požadavek a na základě rolí přístupů, které jsou podporovány službou Azure AD. Pro podmnožinu zaměstnanci nebo spravovaných systémech může být vhodné obchodní automatizaci přístupu pomocí přiřazení na základě rolí. Business může také zpracovat všechny ostatní požadavky na přístup nebo výjimky prostřednictvím modelu na základě požadavku. Některé podniky mohou začínat ruční přiřazení a rozvíjet směrem k hybridního modelu, s záměr nasazení plně založené na rolích na budoucí dobu.

Jiné společnosti, můžou najít nepraktické pro obchodní důvody k dosažení dokončení zřizování na základě rolí a cílit s hybridním přístupem jako požadovaného cíle. Stále jiných společností mohou být spokojeni s jediným zřizování na základě požadavku a nebudete chtít investovat další úsilí k definování a správa na základě rolí, automatizované zřizování zásad.

## <a name="license-management"></a>Správa licencí
Správa licencí na základě skupin ve službě Azure AD umožňuje správcům přiřadit uživatele do skupiny zabezpečení a Azure AD automaticky přiřadí licence všem členům skupiny. Pokud uživatel je následně přidány do nebo ze skupiny odebrány, licence se automaticky přiřazení nebo odebrání podle potřeby.

Můžete použít skupiny, kterou synchronizujete z místní služby AD nebo spravovat ve službě Azure AD. Párování to s Azure AD premium, samoobslužné správy skupin můžete snadno delegovat přiřazení licencí pro příslušné rozhodování. Můžete si být jistí, že problémy, jako je licence konflikty a chybějící data o poloze jsou automaticky řazena navýšení kapacity.

## <a name="self-regulating-user-administration"></a>Správa samoobslužných regulační uživatele
Jakmile vaše organizace zřizovat prostředky ve všech organizacích interní, implementujete vlastní regulační funkce správy uživatelů. Dobré si uvědomit, jaké výhody a výhody zřizování uživatelů napříč hranicemi organizace. V tomto prostředí změna stav uživatele, se automaticky projeví v přístupová práva napříč hranicemi organizace a zeměpisných oblastech. Můžete snížit náklady na zřizování a zjednodušte procesy přístup a schválení. Implementace realizuje celý potenciál implementace řízení přístupu na základě rolí pro správu přístupu k začátku do konce ve vaší organizaci. Můžete snížit náklady na správu prostřednictvím automatizované postupy pro řízení zřizování uživatelů. Můžete zlepšit zabezpečení díky automatizaci vynucení zásad zabezpečení a zjednodušit a centralizovat správu životního cyklu uživatelských a zřizování prostředků pro rozsáhlé uživatelské populace.

> [!NOTE]
> Další informace najdete v tématu Nastavení služby Azure AD pro správu přístupu k samoobslužným aplikacím
> 
> 

Na základě licence (využívajících nárok) Azure AD služby práce podle aktivaci předplatného ve vašem tenantovi Azure AD directory nebo služby. Až bude předplatné k active schopnosti služeb můžou správci adresáře nebo služby spravovat a používají licencovaní uživatelé. 

## <a name="integration-with-other-3rd-party-providers"></a>Integrace s jinými poskytovateli 3. stran

Azure Active Directory poskytuje jednotné přihlašování na a rozšířeného zabezpečení aplikace přístup k tisícům aplikací SaaS a místních webových aplikací. Další informace najdete v tématu [integrace aplikací s Azure Active Directory](../develop/quickstart-v1-integrate-apps-with-azure-ad.md)

## <a name="define-synchronization-management"></a>Definování správy synchronizace
Integrace místních adresářů se službou Azure AD zvyšuje produktivitu uživatelů tím, že jim poskytuje společnou identitu pro přístup ke cloudovým i místním prostředkům. Tato integrace uživatelům a organizacím můžete využít následující:

* Uživatelé se společnou identitou hybridní můžou organizace poskytovat přes místní nebo cloudové služby využívající Windows Server Active Directory a následným připojením k Azure Active Directory.
* Správci můžou poskytnout podmíněný přístup na základě prostředků aplikace, zařízení a identitu uživatele, umístění v síti a ověřování službou Multi-Factor Authentication.
* Uživatelé můžou využívat jejich společné identity pomocí účtů v Azure AD a Office 365, Intune, aplikacím SaaS a aplikace třetích stran.
* Vývojáři mohou vytvářet aplikace, které využívají společný model identit integrace aplikace do místní služby Active Directory nebo Azure pro aplikace založené na cloudu

Na následujícím obrázku je příklad souhrnný přehled procesu synchronizace identit.

![Sync](./media/plan-hybrid-identity-design-considerations/identitysync.png)

Procesu synchronizace identit

Projděte si následující tabulku k porovnání vybrané možnosti synchronizace:

| Možnost správy synchronizace | Výhody | Nevýhody |
| --- | --- | --- |
| Na základě synchronizace (prostřednictvím DirSync nebo AADConnect) |Uživatelé a skupiny synchronizované z místní prostředí a cloudu <br>  **Řízení pomocí zásad**: Je možné nastavit zásady účtů prostřednictvím služby Active Directory, která umožňuje správci spravovat zásady pro hesla, pracovní stanice, omezení, uzamčení ovládací prvky a další, aniž byste museli provádět další úkoly v cloudu.  <br>  **Řízení přístupu**: Můžete omezit přístup ke cloudové službě, aby služby přístupné prostřednictvím podnikové prostředí prostřednictvím online servery, nebo obojí. <br>  Omezit volání podpory: Pokud uživatelé používají méně hesel, která mějte na paměti, jsou méně pravděpodobné, že zapomenete je. <br>  Zabezpečení: Identity uživatelů a informace jsou chráněny, protože všechny servery a služby použité v jednotného přihlašování, standardní a řídit místní. <br>  Podpora pro silné ověřování: Silné ověřování (také nazývané dvoufaktorové ověřování) můžete použít s cloudovou službou. Nicméně pokud používáte silné ověřování, musíte použít jednotné přihlašování. | |
| Federační (prostřednictvím služby AD FS) |Zajišťuje služba tokenů zabezpečení (STS). Při konfiguraci služby tokenů zabezpečení zajistit přístup pomocí jednotného přihlašování s cloudovou službou Microsoftu, budete vytvářet federovaného vztahu důvěryhodnosti mezi vaší místní služby STS a federovanou doménu, kterou jste zadali ve vašem tenantovi Azure AD. <br> Umožňuje koncovým uživatelům získat přístup k více prostředkům používá stejnou sadu přihlašovacích údajů <br>koncoví uživatelé nemusíte udržovat několik sad přihlašovacích údajů. Zatím, uživatelé musí zadat své přihlašovací údaje pro každé z nich zúčastněných prostředků., B2B a B2C scénáře podporované. |Vyžaduje specializované pracovníky pro nasazení a údržby vyhrazené místní servery služby AD FS. Existují omezení týkající se použití silné ověřování, pokud plánujete použití služby AD FS pro vaši službu tokenů zabezpečení. Další informace najdete v tématu [konfigurace rozšířené možnosti pro službu AD FS 2.0](https://go.microsoft.com/fwlink/?linkid=235649). |

> [!NOTE]
> Další informace najdete v tématu [integrace místních identit s Azure Active Directory](whatis-hybrid-identity.md).
> 
> 

## <a name="see-also"></a>Viz také
[Přehled aspektů návrhu](plan-hybrid-identity-design-considerations-overview.md)

